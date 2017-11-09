---
title: "Anexar um disco a uma VM do Azure clássico | Microsoft Docs"
description: "Anexar um disco de dados para uma máquina virtual do Windows criada com o modelo de implementação clássica e inicializá-lo."
services: virtual-machines-windows, storage
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: be4e3e74-05bc-4527-969f-84f10a1d66a7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cynthn
ms.openlocfilehash: 0440cd2fadffd945c75d1b94df67e0c8b8bf7c1e
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Anexar um disco de dados a uma máquina virtual do Windows criada com o modelo de implementação clássica

Este artigo mostra como anexar discos novos e existentes criados com o modelo de implementação clássica para uma máquina virtual do Windows no portal do Azure.



Também pode [anexar um disco de dados para uma VM com Linux no portal do Azure](../../linux/attach-disk-portal.md).

Antes de ligar um disco, consulte estas sugestões:

* O tamanho da máquina virtual controla quantos discos de dados, pode anexar. Para obter mais informações, consulte [tamanhos das virtual machines](../../virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Para utilizar o armazenamento Premium, precisa de uma máquina virtual-série DS ou série GS. Pode utilizar os discos das contas do storage Standard e Premium com estas máquinas virtuais. Armazenamento Premium está disponível em determinados regiões. Para obter mais informações, consulte [Premium Storage: armazenamento de elevado desempenho para cargas de trabalho de Máquina Virtual de Azure](../premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Para um novo disco, não precisa de criar primeiro, porque o Azure cria-lo quando a ligá-lo.

Também pode [anexar um disco de dados com o Powershell](../../virtual-machines-windows-attach-disk-ps.md).

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="find-the-virtual-machine"></a>Localizar a máquina virtual
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione a máquina virtual de recurso indicado no dashboard.
3. No painel esquerdo em **definições**, clique em **discos**.

    ![Abra as definições do disco](./media/attach-disk/virtualmachinedisks.png)

Continue a seguir as instruções para anexar a uma [novo disco](#option-1-attach-a-new-disk) ou um [disco existente](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-and-initialize-a-new-disk"></a>Opção 1: Ligar e iniciar um novo disco

1. No **discos** painel, clique em **anexar novo**.
2. Reveja as definições predefinidas, atualizar conforme necessário e, em seguida, clique em **OK**.

   ![Reveja as definições do disco](./media/attach-disk/attach-new.png)

3. Depois do Azure cria o disco e anexa-lo para a máquina virtual, o novo disco está listado nas definições de disco da máquina virtual em **discos de dados**.

### <a name="initialize-a-new-data-disk"></a>Inicializar um novo disco de dados

1. Ligar à máquina virtual. Para obter instruções, consulte [como ligar e iniciar sessão a uma máquina virtual do Azure com o Windows](../../virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Depois de iniciar sessão para a máquina virtual, abra **Gestor de servidor**. No painel esquerdo, selecione **File and Storage Services**.

    ![Abra o Gestor de servidor](../media/attach-disk-portal/fileandstorageservices.png)

3. Selecione **discos**.
4. O **discos** secção lista os discos. Normalmente, uma máquina virtual tem o disco 0, o disco 1 e o disco 2. Disco 0 é o disco de sistema operativo, disco 1 é o disco temporário e disco 2 é o disco de dados recentemente anexado à máquina virtual. O disco de dados apresenta uma lista de partição como **desconhecido**.

 Clique no disco e selecione **inicializar**.

5. Está a notificado de que todos os dados serão eliminados quando o disco se encontra inicializado. Clique em **Sim** para reconhecer o aviso e inicializar o disco. Depois de concluído, a partição será listada como **GPT**. Clique novamente no disco e selecione **Novo Volume**.

6. Conclua o assistente utilizando os valores predefinidos. Quando o assistente é concluído, o **Volumes** secção lista o novo volume. O disco está agora online e pronto para armazenar dados.

    ![Volume foi inicializado com êxito](./media/attach-disk/newdiskafterinitialization.png)

## <a name="option-2-attach-an-existing-disk"></a>Opção 2: Anexar um disco existente
1. No **discos** painel, clique em **anexar existente**.
2. Em **anexar o disco existente**, clique em **localização**.

   ![Anexar o disco existente](./media/attach-disk/attachexistingdisksettings.png)
3. Em **contas do Storage**, selecione a conta e contentor que retém o ficheiro. vhd.

   ![Localizar a localização do VHD](./media/attach-disk/existdiskstorageaccountandcontainer.png)

4. Selecione o ficheiro. vhd.
5. Em **anexar o disco existente**, o ficheiro que selecionou apenas está listado em **ficheiro VHD**. Clique em **OK**.
6. Depois de Azure anexa o disco à máquina virtual, este está listado nas definições de disco da máquina virtual em **discos de dados**.

## <a name="use-trim-with-standard-storage"></a>Utilizar operações de COMPACTAÇÃO com o armazenamento standard

Se utilizar o armazenamento standard (HDD), deverá ativar a limitação. Operações de COMPACTAÇÃO elimina os blocos no disco, é-lhe faturado apenas para o armazenamento que está a utilizar, na verdade, de modo. Utilizar operações de COMPACTAÇÃO pode poupar custos, incluindo os blocos que resultam de eliminação de ficheiros grandes.

Pode executar este comando para verificar a definição de COMPACTAÇÃO. Abra uma linha de comandos na sua VM do Windows e o tipo:

```
fsutil behavior query DisableDeleteNotify
```

Se o comando devolve 0, cortar está ativada corretamente. Se devolve 1, execute o seguinte comando para ativar a limitação:
```
fsutil behavior set DisableDeleteNotify 0
```

## <a name="next-steps"></a>Passos seguintes
Se a aplicação tem de utilizar o d: disco para armazenar dados, pode [alterar a letra de unidade de disco temporário Windows](../../virtual-machines-windows-change-drive-letter.md).

## <a name="additional-resources"></a>Recursos adicionais
[Sobre discos e VHD para as máquinas virtuais](../../virtual-machines-linux-about-disks-vhds.md)
