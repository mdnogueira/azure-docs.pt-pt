---
title: Anexar um disco a uma VM com Linux no Azure | Microsoft Docs
description: "Saiba como ligar um disco de dados para uma VM com Linux utilizando o modelo de implementação clássica e inicializar o disco para que fique pronta para ser utilizado"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 4901384d-2a6f-4f46-bba0-337a348b7f87
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 017ba7197e11c2b222082833d5acabb9e542b762
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Como anexar um disco de dados para uma Máquina Virtual Linux
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Veja como [anexar um disco de dados utilizando o modelo de implementação Resource Manager](../add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pode anexar vazios discos e os discos que contêm dados para as VMs do Azure. Ambos os tipos de discos são ficheiros. vhd que residem numa conta de armazenamento do Azure. Como a adicionar qualquer disco a uma máquina de Linux, depois de anexar o disco tem de inicializar e formatá-la para que fique pronta para ser utilizado. Este detalhes de artigo anexar discos vazios e discos já que contém os dados para as suas VMs, bem como para, em seguida, inicializar e formatar um novo disco.

> [!NOTE]
> É uma melhor prática para utilizar um ou mais discos separados para armazenar dados de uma máquina virtual. Quando cria uma máquina virtual do Azure, tem um disco de sistema operativo e um disco temporário. **Não utilize o disco temporário para armazenar dados persistentes.** Como o nome indica, fornece apenas armazenamento temporário. Não oferece nenhuma cópia de segurança ou redundância porque esta não reside no armazenamento do Azure.
> O disco temporário é normalmente gerido pelo agente Linux do Azure e montado automaticamente para **/mnt/recursos** (ou **/mnt** nas imagens Ubuntu). Por outro lado, um disco de dados pode ser denominado pelo Linux kernel semelhante ao seguinte `/dev/sdc`, e terá de partição, formatar e monte este recurso. Consulte o [guia de utilizador de agente do Azure Linux] [ Agent] para obter mais detalhes.
> 
> 

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Inicializar um novo disco de dados no Linux
1. SSH para a VM. Para obter mais informações, consulte [como iniciar sessão para uma máquina virtual com Linux][Logon].
2. Em seguida tem de localizar o identificador de dispositivo para o disco de dados de inicialização. Existem duas formas de fazê-lo:
   
    a) Grep para dispositivos SCSI nos registos, como o seguinte comando:
   
    ```bash
    sudo grep SCSI /var/log/messages
    ```
   
    Para as distribuições do Ubuntu recentes, poderá ter de utilizar `sudo grep SCSI /var/log/syslog` porque o registo para `/var/log/messages` podem estar desativados por predefinição.
   
    Pode encontrar o identificador do disco de dados último que foi adicionado nas mensagens que são apresentadas.
   
    ![Obter as mensagens de disco](./media/attach-disk/scsidisklog.png)
   
    OU
   
    b) utilize o `lsscsi` comando para determinar o id de dispositivo. `lsscsi` podem ser instaladas por um `yum install lsscsi` (no Red Hat com base distribuições) ou `apt-get install lsscsi` (no Debian com base distribuições). Pode encontrar o disco que está procurando pelo respetivo *lun* ou **número de unidade lógica**. Por exemplo, o *lun* para os discos ligados que podem ser facilmente vistos a partir de `azure vm disk list <virtual-machine>` como:

    ```azurecli
    azure vm disk list myVM
    ```

    O resultado é semelhante ao seguinte:

    ```azurecli
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                         OS
    data:    ---  --------  --------------------------------  -----
    data:         30        myVM-2645b8030676c8f8.vhd  Linux
    data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
    info:    vm disk list command OK
    ```
   
    Comparar estes dados com o resultado do `lsscsi` para a mesma máquina virtual de exemplo:
   
    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```
   
    O último número na cadeia de identificação em cada linha é o *lun*. Consulte `man lsscsi` para obter mais informações.
3. Na linha de comandos, escreva o seguinte comando para criar o seu dispositivo:
   
    ```bash
    sudo fdisk /dev/sdc
    ```

4. Quando lhe for pedido, escreva  **n**  para criar uma partição.

    ![Criar dispositivo](./media/attach-disk/fdisknewpartition.png)

5. Quando lhe for pedido, escreva **p** para efetuar a partição a partição primária. Tipo **1** para torná-lo a primeira partição e o tipo, em seguida, introduza para aceitar o valor predefinido para o cilindro. Em alguns sistemas, esta operação pode mostrar os valores predefinidos do primeiro e os último setores, em vez do cilindro. Pode escolher aceitar estas predefinições.

    ![Criar a partição](./media/attach-disk/fdisknewpartdetails.png)


6. Tipo **p** para ver os detalhes sobre o disco que está a ser particionado.

    ![Informações do disco de lista](./media/attach-disk/fdiskpartitiondetails.png)


7. Tipo **w** para escrever as definições para o disco.

    ![Escrever as alterações de disco](./media/attach-disk/fdiskwritedisk.png)

8. Agora, pode criar o sistema de ficheiros na partição de novo. Acrescentar o número de partição para o ID de dispositivo (no exemplo seguinte `/dev/sdc1`). O exemplo seguinte cria uma partição ext4 no /dev/sdc1:
   
    ```bash
    sudo mkfs -t ext4 /dev/sdc1
    ```
   
    ![Criar o sistema de ficheiros](./media/attach-disk/mkfsext4.png)
   
   > [!NOTE]
   > Sistemas SuSE Linux Enterprise 11 só suportam acesso só de leitura ext4 sistemas de ficheiros. Para estes sistemas, é recomendado para formatar o novo sistema de ficheiros como ext3 em vez de ext4.

9. Efetue um diretório para montar o novo sistema de ficheiros, da seguinte forma:
   
    ```bash
    sudo mkdir /datadrive
    ```

10. Por fim, pode montar a unidade, da seguinte forma:
   
    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```
   
    O disco de dados está agora pronto para utilizar como **/datadrive**.
   
    ![Criar o diretório e montar o disco](./media/attach-disk/mkdirandmount.png)

11. Adicione nova unidade à /etc/fstab:
   
    Para garantir que a unidade é remontadas da automaticamente após um reinício tem de ser adicionado no ficheiro etc/fstab. Além disso, é vivamente recomendado que o UUID (universalmente Unique IDentifier) é utilizado numa /etc/fstab referir-se a unidade em vez de apenas o nome de dispositivo (ou seja, /dev/sdc1). Utilizar o UUID evita a ser montado numa localização especificada, se o sistema operativo Deteta um erro de disco durante o arranque e de quaisquer discos de dados restantes, em seguida, que está a ser atribuídos esses IDs do dispositivo de disco incorreta. Para localizar o UUID da nova unidade, pode utilizar o **blkid** utilitário:
   
    ```bash
    sudo -i blkid
    ```
   
    O resultado semelhante ao seguinte exemplo:
   
    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

    > [!NOTE]
    > Editar incorretamente o **etc/fstab** ficheiros poderia resultar num sistema de arranque. Se não souber, consulte a documentação de distribuição para obter informações sobre como editar corretamente este ficheiro. Também é recomendável que uma cópia de segurança do ficheiro /etc/fstab foi criada antes de editar.

    Em seguida, abra o **etc/fstab** ficheiro num editor de texto:

    ```bash
    sudo vi /etc/fstab
    ```

    Neste exemplo, utilizamos o valor UUID para o novo **/dev/sdc1** dispositivo que foi criado nos passos anteriores e a pontodemontagem **/datadrive**. Adicione a seguinte linha ao final do **etc/fstab** ficheiro:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
    ```

    Ou, em sistemas com base no SuSE Linux poderá ter de utilizar um formato ligeiramente diferente:

    ```sh
    /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    ```

    > [!NOTE]
    > O `nofail` opção garante que a VM entrar, mesmo se o sistema de ficheiros está danificado ou o disco não existe no momento do arranque. Sem esta opção, pode encontrar o comportamento, conforme descrito em [não é possível SSH para a VM com Linux devido a erros FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Pode agora testar que o sistema de ficheiros está montado corretamente pela desmontagem e remontar, em seguida, o sistema de ficheiros, ou seja, utilizando o exemplo ponto de montagem `/datadrive` criou nos passos anteriores:

    ```bash
    sudo umount /datadrive
    sudo mount /datadrive
    ```

    Se o `mount` comando produz erro, verifique o ficheiro de fstab etc/para a sintaxe correta. Se as unidades de dados adicionais ou partições são criadas, introduzi-las em etc/fstab separadamente bem.

    Faz com que a unidade gravável utilizando este comando:

    ```bash
    sudo chmod go+w /datadrive
    ```

    > [!NOTE]
    > Posteriormente, remover um disco de dados sem necessitar de editar fstab pode fazer com que a VM falhar efetuar o arranque. Se se tratar de uma ocorrência comum, a maioria das distribuições fornecem um o `nofail` e/ou `nobootwait` fstab opções que permitem que um sistema de arranque, mesmo se o disco não consegue montar no tempo de arranque. Consulte a documentação de sua distribuição para obter mais informações sobre estes parâmetros.

### <a name="trimunmap-support-for-linux-in-azure"></a>Suporte de cortar/UNMAP para Linux no Azure
Alguns kernels Linux suportam operações de cortar/UNMAP para eliminar os blocos no disco. Estas operações são principalmente útil para armazenamento standard para informar o Azure eliminado páginas já não são válido e pode ser eliminada. Rejeitar páginas pode guardar o custo, se criar ficheiros grandes e, em seguida, elimine-os.

Existem duas formas de ativar a limitação suportam na sua VM com Linux. Normalmente, consulte a distribuição para a abordagem recomendada:

* Utilize o `discard` montar opção na `/etc/fstab`, por exemplo:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```

* Em alguns casos o `discard` opção pode ter implicações de desempenho. Em alternativa, pode executar o `fstrim` manualmente de comando na linha de comandos ou adicioná-lo à sua crontab regularmente a executar:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**
  
    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Resolução de problemas
[!INCLUDE [virtual-machines-linux-lunzero](../../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Passos Seguintes
Pode ler mais sobre como utilizar a VM com Linux nos seguintes artigos:

* [Como iniciar sessão para uma máquina virtual com Linux][Logon]
* [Como desligar um disco de uma máquina virtual Linux](detach-disk.md)
* [Utilizar a CLI do Azure com o modelo de implementação clássica](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Configurar o RAID numa VM com Linux no Azure](../configure-raid.md)
* [Configurar LVM numa VM com Linux no Azure](../configure-lvm.md)

<!--Link references-->
[Agent]:../agent-user-guide.md
[Logon]:../mac-create-ssh-keys.md
